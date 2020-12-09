---
title: Desinstalar Ad Connect Azure
description: Este documento descreve como desinstalar o Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934188"
---
# <a name="uninstall-azure-ad-connect"></a>Desinstalar Ad Connect Azure

Este documento descreve como desinstalar corretamente o Azure AD Connect.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>Desinstalar Azure AD Connect a partir do servidor
A primeira coisa que precisa de fazer é remover o Azure AD Connect do servidor em que está a funcionar.  Utilize os passos seguintes:

 1. No servidor que executa o Azure AD Connect, navegue para **o Painel de Controlo**.
 2. Clique **em Desinstalar um programa** 
  ![ Desinstale um programa](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. Selecione **Azure Ad Connect**.
 ![Selecione Azure Ad Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. Quando solicitado, clique em **Sim** para confirmar.
 5. Esta confirmação irá trazer para cima o ecrã Azure AD Connect.  Clique em **Remover**.
 ![Remove](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. Assim que esta ação estiver concluída, clique em **Sair**.
 7. ![Sair](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. Voltando ao **Painel de Controlo** clique em **Refresh** e todos os componentes devem ter sido removidos.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
- [Instalar o Azure AD Connect com uma base de dados ADSync existente](how-to-connect-install-existing-database.md)
- [Instalar o Azure AD Connect com permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)

