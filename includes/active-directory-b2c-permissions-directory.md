---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: dfe35eecfec5a12395ac3d9973a651e033aeea73
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672540"
---
#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/) 

1. Em **Gestão**, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Selecione o **separador APIs** da Microsoft e, em seguida, selecione **o Gráfico do Microsoft**.
1. Selecione **permissões de aplicação**.
1. Expanda o grupo de permissão apropriado e selecione a caixa de verificação da permissão para conceder ao seu pedido de gestão. Por exemplo:
    * **Auditoria**  >  **AuditLog.Read.All**: Para ler os registos de auditoria do diretório.
    * **Diretório**  >  **Diretório.ReadWrite.All**: Para cenários de migração de utilizadores ou de gestão de utilizadores.
    * **Política**  >  **Policy.ReadWrite.TrustFramework**: Para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, a implementação de políticas personalizadas com a Azure Pipelines.
1. **Selecione Permissões de adicionar**. Como dirigido, aguarde alguns minutos antes de seguir para o próximo passo.
1. Selecione **o consentimento administrativo do Grant para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que foi atribuída pelo menos a função de administrador de *aplicação cloud.*
1. Selecione **Aceitar**.
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
1. Selecione **permissões grant**e, em seguida, selecione **Sim**. Pode levar alguns minutos para que as permissões se propaguem completamente.
