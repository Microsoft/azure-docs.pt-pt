---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/05/2021
ms.author: mimart
ms.openlocfilehash: 5a2382146cd8b85b8eef54b924a206dda7107b0f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382470"
---
#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/) 

1. Em **Gestão**, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Selecione o **separador APIs** da Microsoft e, em seguida, selecione **o Gráfico do Microsoft**.
1. Selecione **permissões de aplicação**.
1. Expanda o grupo de permissão apropriado e selecione a caixa de verificação da permissão para conceder ao seu pedido de gestão. Por exemplo:
    * **Utilizador**  >  **Utilizador.ReadWrite.All**: Para cenários de migração de utilizadores ou de gestão de utilizadores.
    * **Grupo**  >  **Grupo.ReadWrite.All**: Para criar grupos, leia e atualize membros do grupo e elimine grupos.
    * **Auditoria**  >  **AuditLog.Read.All**: Para ler os registos de auditoria do diretório.
    * **Política**  >  **Policy.ReadWrite.TrustFramework**: Para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, a implementação de políticas personalizadas com a Azure Pipelines.
1. **Selecione Permissões de adicionar**. Como dirigido, aguarde alguns minutos antes de seguir para o próximo passo.
1. Selecione **o consentimento administrativo do Grant para (o nome do seu inquilino)**.
1. Se não tiver sido inscrito na conta global do Administrador, inscreva-se com uma conta no seu inquilino Azure AD B2C que foi atribuída pelo menos a função de administrador de *aplicação cloud* e, em seguida, selecione **Grant a cargo de administrador para (o nome do seu inquilino)**.
1. Selecione **Refresh**, e, em seguida, verifique que "Concedido para..." aparece em **Estado**. Pode levar alguns minutos para as permissões se propagarem.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Na página geral da **aplicação registada,** selecione **Definições**.
1. No **Acesso a API,** selecione **permissões necessárias.**
1. Selecione **Microsoft Graph**.
1. Ao abrigo **de Permissões de Aplicação,** selecione a caixa de verificação da permissão para conceder à sua aplicação de gestão. Por exemplo:
    * **Leia todos os dados do registo de auditoria**: Selecione esta permissão para ler os registos de auditoria do diretório.
    * **Leia e escreva dados do diretório**: Selecione esta permissão para cenários de migração de utilizadores ou de gestão de utilizadores.
    * **Leia e escreva as políticas de enquadramento de confiança da sua organização**: Selecione esta permissão para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, a implementação de políticas personalizadas com a Azure Pipelines.
1. Selecione **Guardar**.
1. Selecione **permissões grant** e, em seguida, selecione **Sim**. Pode levar alguns minutos para que as permissões se propaguem completamente.
