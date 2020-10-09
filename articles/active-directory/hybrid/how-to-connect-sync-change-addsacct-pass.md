---
title: 'Azure AD Connect sync: Alterar a palavra-passe da conta AD DS Microsoft Docs'
description: Este documento de tópico descreve como atualizar o Azure AD Connect após a alteração da palavra-passe da conta DS AD.
services: active-directory
keywords: Conta DS AD, conta Ative Directory, senha
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85357516"
---
# <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe da conta do AD DS
A conta DS AD refere-se à conta de utilizador utilizada pelo Azure AD Connect para comunicar com o Ative Directory no local. Se alterar a palavra-passe da conta DS AD, tem de atualizar o Serviço de Sincronização AD Connect Com a nova palavra-passe. Caso contrário, a Sincronização já não pode sincronizar corretamente com o Ative Directory no local e encontrará os seguintes erros:

* No Gestor de Serviços de Sincronização, qualquer operação de importação ou exportação com AD no local falha com erro **de credenciais de arranque.**

* No Windows Event Viewer, o registo do evento de aplicação contém um erro com **o ID do evento 6000** e a mensagem **"O agente de gestão "contoso.com" não executou porque as credenciais eram inválidas.**


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Como atualizar o Serviço de Sincronização com nova senha para conta DS AD
Para atualizar o Serviço de Sincronização com a nova senha:

1. Iniciar o Gestor de Serviços de Sincronização (START → Serviço de Sincronização).
</br>![Gestor de Serviço sincronizado](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Vá ao **separador Conectores.**

3. Selecione o **Conector AD** que corresponde à conta DS AD para a qual a sua palavra-passe foi alterada.

4. Em **Ações**, selecione **Properties**.

5. No diálogo pop-up, selecione **Connect to Ative Directory Forest**:

6. Introduza a nova palavra-passe da conta DS AD na caixa de texto **password.**

7. Clique **em OK** para guardar a nova palavra-passe e feche o diálogo pop-up.

8. Reinicie o Serviço de Sincronização AZure AD Connect Syncsinsind. Isto é para garantir que qualquer referência à senha antiga é removida da cache de memória.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
