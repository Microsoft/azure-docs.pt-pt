---
title: Azure AD Connect Health instruções de recuperação de dados Microsoft Docs
description: Esta página descreve como recuperar dados do Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463656"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Azure AD Ligar instruções de saúde para a recuperação de dados

Este documento descreve como utilizar o Azure AD Connect para obter dados da Azure AD Connect Health.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Recupere todos os endereços de e-mail para os utilizadores configurados para alertas de saúde.

Para recuperar os endereços de e-mail para todos os seus utilizadores configurados no Azure AD Connect Health para receber alertas, utilize os seguintes passos.

1.  Comece no Azure Ative Directory Connect blade e selecione **Sync Services** a partir da barra de navegação à esquerda.
 ![Serviços de Sincronização](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  Clique no azulejo **alerta.**</br>
 ![Alerta](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  Clique em **Definições de Notificação**.
 ![Notificação](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  Na lâmina **'Definição de Notificação',** encontrará a lista de endereços de e-mail que foram ativados como destinatários para notificações de alerta sanitário.
 ![E-mails](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>Recuperar contas que foram sinalizadas com tentativas de Má Senha da AD FS

Para recuperar contas que foram sinalizadas com tentativas de Má Senha da AD FS, utilize os seguintes passos.

1.  A partir da lâmina Azure Ative Directory Health, selecione **Sync Errors**.
 ![Erros de sincronização](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  Na lâmina **Sync Errors,** clique em **Exportação**. Isto exportará uma lista dos erros de sincronização registados.
 ![Exportarar](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Passos Seguintes
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente do Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operações do Azure AD Connect Health](how-to-connect-health-operations.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Azure AD Connect Health Version History](reference-connect-health-version-history.md)