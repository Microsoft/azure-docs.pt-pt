---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82900372"
---
1. Inscreva-se na [Consola de Desenvolvimento](https://developer.amazon.com/dashboard) da Amazon com as suas credenciais de conta Amazon.
1. Se ainda não o fez, clique em **Registar-se,** siga as etapas de registo do programador e aceite a política.
1. A partir do Painel de Instrumentos, **selecione Login com a Amazon.**
1. Selecione **Criar um novo perfil de segurança**.
1. Introduza um **Nome de Perfil de Segurança,** **Descrição do Perfil de Segurança**e URL de Aviso de Privacidade do **Consentimento**, por `https://www.contoso.com/privacy` exemplo, O URL de aviso de privacidade é uma página que gere que fornece informações de privacidade aos utilizadores. Em seguida, clique em **Guardar**.
1. Na secção **Iniciar sessão com configurações da Amazon,** selecione o **Nome do Perfil de Segurança** criado, clique no ícone **'Gerir'** e selecione **Definições web**.
1. Na secção **Definições Web,** copie os valores do **ID**do Cliente . Selecione **Show Secret** para obter o segredo do cliente e, em seguida, copiá-lo. Precisa de ambos para configurar uma conta Amazon como fornecedor de identidade no seu inquilino. **A Client Secret** é uma importante credencial de segurança.
1. Na secção **Definições Web,** selecione **Editar**. Em **Origens Permitidas** e **URLs de retorno permitidos,** insira os URLs apropriados (referidos acima). 
1. Clique em **Guardar**.
