---
title: O que é a sincronização de hash de palavra-passe com o Azure AD? | Microsoft Docs
description: Descreve a sincronização de hash de palavra-passe.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f588115e38ded07b79568c91b8d166949b34a886
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294951"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>O que é a sincronização de hash de palavra-passe com o Azure AD?
Sincronização de hash de palavra-passe é uma dos métodos de início de sessão utilizados para realizar a identidade híbrida. Azure AD Connect sincroniza um hash, do hash, de uma senha de usuários de uma instância do Active Directory no local para um Azure AD com base na cloud de instância.

Sincronização de hash de palavra-passe é uma extensão para a funcionalidade de sincronização do directory implementada pela sincronização do Azure AD Connect. Pode utilizar esta funcionalidade para iniciar sessão nos serviços do Azure AD como o Office 365. Iniciar sessão utilizando a mesma palavra-passe que utiliza para iniciar sessão sua instância do Active Directory no local serviço.

![O que é o Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

Ajuda a sincronização de hash de palavra-passe, reduzindo o número de palavras-passe, sua necessidade dos utilizadores para manter a apenas uma. Sincronização de hash de palavra-passe pode:

* Aumente a produtividade dos seus utilizadores.
* Reduza os custos de suporte técnico.  

Opcionalmente, pode configurar a sincronização de hash de palavra-passe como uma cópia de segurança se optar por utilizar [federação com os serviços de Federação do Active Directory (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) como método de início de sessão.

Para utilizar a sincronização de hash de palavra-passe no seu ambiente, tem de:

* Instalar o Azure AD Connect.  
* Configure a sincronização de diretórios entre a instância do Active Directory no local e a sua instância do Azure Active Directory.
* Ative a sincronização de hash de palavra-passe.



Para obter mais informações, consulte [o que é a identidade híbrida?](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Próximos Passos

- [O que é a identidade híbrida?](whatis-phs.md)
- [O que é o Azure AD Connect e Connect Health?](whatis-azure-ad-connect.md)
- [O que é a autenticação pass-through (PTA)?](how-to-connect-pta.md)
- [O que é o Federação?](whatis-fed.md)
- [O que é o início de sessão único em?](how-to-connect-sso.md)
- [Como funciona a sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md)
