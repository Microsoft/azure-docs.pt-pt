---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900372"
---
1. Inscreva-se na [Consola de Desenvolvimento da Amazon](https://developer.amazon.com/dashboard) com as suas credenciais de conta Amazon.
1. Se ainda não o fez, clique em **Sign Up,** siga os passos de registo do programador e aceite a apólice.
1. A partir do Dashboard, selecione **Login com Amazon**.
1. Selecione **Criar um novo perfil**de segurança .
1. Introduza um nome de perfil de **segurança,** **descrição**do perfil de segurança e URL de aviso de privacidade de **consentimento,** por exemplo, `https://www.contoso.com/privacy` o URL do aviso de privacidade é uma página que gere que fornece informações de privacidade aos utilizadores. Em seguida, clique em **Guardar**.
1. Na secção **Login com Configurações Amazon,** selecione o Nome do Perfil de **Segurança** que criou, clique no ícone **'Gerir'** e selecione **Configurações Web**.
1. Na secção **Definições Web,** copie os valores do ID do **Cliente**. Selecione **Show Secret** para obter o segredo do cliente e, em seguida, copiá-lo. Você precisa de ambos para configurar uma conta Amazon como um fornecedor de identidade no seu inquilino. **Client Secret** é uma importante credencial de segurança.
1. Na secção **Definições Web,** selecione **Editar**. Nas **Origens Permitidas** e **NOS URLs de Retorno Permitidos,** introduza os URLs apropriados (acima indicado). 
1. Clique em **Guardar**.
