---
title: Mapeamento de declarações do usuário de colaboração B2B-Azure Active Directory
description: Personalize as declarações de usuário emitidas no token SAML para usuários B2B do Azure Active Directory (Azure AD).
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273189"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapeamento de declarações do usuário de colaboração B2B no Azure Active Directory

O Azure Active Directory (AD do Azure) dá suporte à personalização das declarações emitidas no token SAML para usuários de colaboração B2B. Quando um usuário é autenticado no aplicativo, o Azure AD emite um token SAML para o aplicativo que contém informações (ou declarações) sobre o usuário que os identifica exclusivamente. Por padrão, isso inclui o nome de usuário do usuário, o endereço de email, o nome e o sobrenome.

No [portal do Azure](https://portal.azure.com), você pode exibir ou editar as declarações que são enviadas no token SAML para o aplicativo. Para acessar as configurações, selecione **Azure Active Directory** > **aplicativos empresariais** > o aplicativo configurado para logon único > logon **único**. Consulte as configurações de token SAML na seção **atributos de usuário** .

![Mostra os atributos de token SAML na interface do usuário](media/claims-mapping/view-claims-in-saml-token.png)

Há dois motivos possíveis para que você precise editar as declarações emitidas no token SAML:

1. O aplicativo requer um conjunto diferente de URIs de declaração ou valores de declaração.

2. O aplicativo requer que a declaração NameIdentifier seja algo diferente do nome principal do usuário (UPN) armazenado no Azure AD.

Para obter informações sobre como adicionar e editar declarações, consulte [Personalizando declarações emitidas no token SAML para aplicativos empresariais no Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Para usuários de colaboração B2B, o mapeamento de NameID e o UPN entre locatários são impedidos por motivos de segurança.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre as propriedades de usuário de colaboração B2B, consulte [Propriedades de um usuário de colaboração Azure Active Directory B2B](user-properties.md).
- Para obter informações sobre tokens de usuário para usuários de colaboração B2B, consulte [entender tokens de usuário na colaboração B2B do Azure ad](user-token.md).

