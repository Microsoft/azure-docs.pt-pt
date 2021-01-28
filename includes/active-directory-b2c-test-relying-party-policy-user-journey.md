---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 4ca47bf5b99588fbe4efd91a9211ee0309c1892c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951543"
---
## <a name="test-your-custom-policy"></a>Teste a sua política personalizada

1. Selecione a sua política partidária de dependência, por exemplo `B2C_1A_signup_signin` .
1. Para **Aplicação**, selecione uma aplicação web que já [se registou anteriormente.](../articles/active-directory-b2c/troubleshoot-custom-policies.md#troubleshoot-the-runtime) A **URL de resposta** deve mostrar `https://jwt.ms` .
1. Selecione o botão **Executar agora.**

Se o processo de inscrição for bem sucedido, o seu navegador é redirecionado para `https://jwt.ms` , que exibe o conteúdo do token devolvido pelo Azure AD B2C.

