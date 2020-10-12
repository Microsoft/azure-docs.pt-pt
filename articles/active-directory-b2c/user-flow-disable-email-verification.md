---
title: Desativar a verificação de e-mail durante a inscrição do cliente
titleSuffix: Azure AD B2C
description: Saiba como desativar a verificação de e-mail durante a inscrição do cliente no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10613bd2d6219272248f882e45ae1c33d2cc9d61
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384214"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desativar a verificação de e-mail durante a inscrição do cliente no Azure Ative Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Siga estes passos para desativar a verificação de e-mail:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro **de subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **fluxos de utilizador**.
1. Selecione o fluxo do utilizador para o qual pretende desativar a verificação de e-mail. Por exemplo, *B2C_1_signinsignup.*
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição de conta local**.
1. Nos **atributos do Utilizador**, selecione Endereço de **E-mail.**
1. No **drop-down de verificação requere,** selecione **Nº**.
1. Selecione **Guardar**. A verificação por e-mail está agora desativada para este fluxo de utilizador.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [personalizar a interface de utilizador no Azure Ative Directory B2C](customize-ui-overview.md)

