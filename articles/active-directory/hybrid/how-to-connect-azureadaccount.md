---
title: Alterar a palavra-passe da conta do conector AZure AD | Microsoft Docs
description: Este tópico documenta como restaurar a conta Azure AD Connector.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e4f31c560fe3dd91689b361ed520e466fd52da1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85360015"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Alterar a palavra-passe da conta do Conector do Azure AD
A conta Azure AD Connector é supostamente gratuita em serviço. Se precisas de redefinir as suas credenciais, então este tópico é para ti. Por exemplo, se um Administrador Global tiver, por engano, redefiniu a palavra-passe na conta utilizando o PowerShell.

## <a name="reset-the-credentials"></a>Redefinir as credenciais
Se a conta Azure AD Connector não puder contactar a Azure AD devido a problemas de autenticação, a palavra-passe pode ser reiniciada.

1. Inicie sedumento no servidor de sincronização Azure AD Connect e inicie o PowerShell.
2. Execute `Add-ADSyncAADServiceAccount`.
   ![Conta de serviços de serviço de powerShell cmdlet addadsyncaadservice](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Forneça credenciais de administração Azure AD Global.

Este cmdlet reinicia a palavra-passe para a conta de serviço e atualizá-la tanto no AD AZure como no motor de sincronização.

## <a name="known-issues-these-steps-can-solve"></a>Questões conhecidas estes passos podem resolver
Esta secção é uma lista de erros reportados por clientes que foram corrigidos por uma credencial reposta na conta Azure AD Connector.

---
Evento 6900 O servidor encontrou um erro inesperado ao processar uma notificação de alteração de palavra-passe: AADSTS70002: Credenciais de validação de erros. AADSTS50054: A palavra-passe antiga é utilizada para autenticação.

---
Erro do evento 659 enquanto recupera a configuração da sincronização da política de palavra-passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Credenciais de validação de erros. AADSTS50054: A palavra-passe antiga é utilizada para autenticação.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
