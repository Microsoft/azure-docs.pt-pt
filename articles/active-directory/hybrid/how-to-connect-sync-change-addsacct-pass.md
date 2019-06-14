---
title: 'Sincronização do Azure AD Connect:  Alterar a palavra-passe de conta do AD DS | Documentos da Microsoft'
description: Este documento de tópico descreve como atualizar do Azure AD Connect, depois da palavra-passe da conta do AD DS é alterada.
services: active-directory
keywords: Conta do AD DS, a conta do Active Directory, a palavra-passe
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60241604"
---
# <a name="changing-the-ad-ds-account-password"></a>Alterar a palavra-passe de conta do AD DS
A conta do AD DS refere-se para a conta de utilizador utilizada pelo Azure AD Connect para comunicar com o Active Directory no local. Se alterar a palavra-passe da conta do AD DS, tem de atualizar o serviço do Azure AD Connect sincronização com a nova palavra-passe. Caso contrário, a sincronização já não pode sincronizar corretamente com o Active Directory no local e que encontrará os seguintes erros:

* A operação do Synchronization Service Manager, qualquer importação ou exportação com locais AD falha com **credenciais de início não** erro.

* No Visualizador de eventos do Windows, o log de eventos do aplicativo contém um erro com **evento ID 6000** e a mensagem **"o agente de gestão"contoso.com"Falha ao executar uma vez que as credenciais eram inválidas"** .


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>Como atualizar o serviço de sincronização com a nova palavra-passe para a conta do AD DS
Para atualizar o serviço de sincronização com a nova palavra-passe:

1. Inicie o Synchronization Service Manager (serviço de sincronização do início →).
</br>![Gestor do serviço de sincronização](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Vá para o **conectores** separador.

3. Selecione o **conector AD** que corresponde à conta do AD DS para o qual a respetiva palavra-passe foi alterada.

4. Sob **ações**, selecione **propriedades**.

5. Na caixa de diálogo pop-up, selecione **ligar à floresta do Active Directory**:

6. Introduza a nova palavra-passe da conta do AD DS no **palavra-passe** caixa de texto.

7. Clique em **OK** para guardar a nova palavra-passe e fechar a caixa de diálogo pop-up.

8. Reinício do Azure AD ligar o serviço de sincronização no Gestor de controlo de serviços do Windows. Isso é para garantir que qualquer referência para a palavra-passe antiga é removida da cache de memória.

## <a name="next-steps"></a>Passos Seguintes
**Tópicos de descrição geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
