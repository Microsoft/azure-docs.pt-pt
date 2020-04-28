---
title: 'Sincronização Azure AD Connect: Alterar a senha da conta AD DS Microsoft Docs'
description: Este documento de tópico descreve como atualizar o Azure AD Connect após a alteração da palavra-passe da conta AD DS.
services: active-directory
keywords: Conta AD DS, conta De Diretório Ativo, senha
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60241604"
---
# <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe da conta do AD DS
A conta AD DS refere-se à conta de utilizador utilizada pelo Azure AD Connect para comunicar com o Diretório Ativo no local. Se alterar a palavra-passe da conta AD DS, tem de atualizar o Serviço de Sincronização de Ligação AD Azure com a nova palavra-passe. Caso contrário, a Sincronização já não pode sincronizar corretamente com o Diretório Ativo no local e encontrará os seguintes erros:

* No Gestor de Serviços de Sincronização, qualquer operação de importação ou exportação com AD no local falha sem erro de **credenciais de início.**

* No Windows Event Viewer, o registo do evento da aplicação contém um erro com o **Id do evento 6000** e a mensagem **'O agente de gestão "contoso.com" não foi executado porque as credenciais eram inválidas.**


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Como atualizar o Serviço de Sincronização com nova senha para conta AD DS
Para atualizar o Serviço de Sincronização com a nova senha:

1. Inicie o Gestor de Serviços de Sincronização (START → Serviço de Sincronização).
</br>![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Vá ao separador **Conectores.**

3. Selecione o **Conector AD** que corresponde à conta AD DS para a qual a sua palavra-passe foi alterada.

4. Em **Ações,** selecione **Propriedades**.

5. No diálogo pop-up, selecione **Connect to Ative Directory Forest:**

6. Introduza a nova palavra-passe da conta AD DS na caixa de texto **password.**

7. Clique **em OK** para guardar a nova senha e fechar o diálogo pop-up.

8. Reiniciar o Serviço de Sincronização de Ligação Azure AD no âmbito do Windows Service Control Manager. Isto é para garantir que qualquer referência à palavra-passe antiga seja removida do cache de memória.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
