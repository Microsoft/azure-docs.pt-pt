---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78184363"
---
#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Na página de visão geral da **aplicação Registada,** selecione **Definições**.
1. No **acesso a API,** selecione **permissões necessárias**.
1. Selecione **Microsoft Graph**.
1. Em **permissões**de pedido, selecione a caixa de verificação da permissão de concessão ao seu pedido de gestão. Por exemplo:
    * **Leia todos os dados do registo de auditoria**: Selecione esta permissão para ler os registos de auditoria do diretório.
    * **Ler e escrever dados**de diretório : Selecione esta permissão para a migração do utilizador ou cenários de gestão do utilizador.
    * **Leia e escreva as políticas-quadro de confiança da sua organização**: Selecione esta permissão para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, implementação de políticas personalizadas com pipelines Azure.
1. Selecione **Guardar**.
1. Selecione **permissões de concessão**e, em seguida, selecione **Sim**. Pode levar alguns minutos para as permissões se propagarem completamente.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Em **Gerir,** selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicionar uma permissão**.
1. Selecione o separador **MICROSOFT APIs** e, em seguida, selecione **Microsoft Graph**.
1. Selecione **permissões de pedido**.
1. Expanda o grupo de permissão apropriado e selecione a caixa de verificação da permissão de concessão ao seu pedido de gestão. Por exemplo:
    * **AuditLog** > **AuditLog.Read.All**: Para ler os registos de auditoria do diretório.
    * **Directory****Diretório.ReadWrite.All**: Para a migração do utilizador ou cenários de gestão do utilizador. > 
    * **Policy** > **Política.ReadWrite.TrustFramework**: Para cenários de integração contínua/entrega contínua (CI/CD). Por exemplo, implementação de políticas personalizadas com pipelines Azure.
1. **Selecione Adicionar permissões**. Como foi dirigido, aguarde alguns minutos antes de passar para o próximo passo.
1. Selecione **Grant administrador consentimento para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que tenha sido atribuída pelo menos a função de administrador de *aplicação Cloud.*
1. Selecione **Aceitar**.
1. Selecione **Refresh**, e, em seguida, verifique se "Granted for ..." aparece em **status**. Pode levar alguns minutos para as permissões se propagarem.
