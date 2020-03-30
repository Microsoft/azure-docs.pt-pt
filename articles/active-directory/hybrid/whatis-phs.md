---
title: O que é a sincronização de hash de senha com a AD Azure? | Microsoft Docs
description: Descreve a sincronização de hash de senha.
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
ms.openlocfilehash: 83e172e61411c7c1c098706b5ff4566f565d6bf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "66253847"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>O que é a sincronização de hash de senha com a AD Azure?
A sincronização de hash de palavra-passe é um dos métodos de entrada usados para alcançar a identidade híbrida. O Azure AD Connect sincroniza um hash, do hash, de uma palavra-passe dos utilizadores de uma instância de Ative Directory no local para uma instância azure ad baseada na nuvem.

A sincronização do hash password é uma extensão da funcionalidade de sincronização do diretório implementada pela sincronização Azure AD Connect. Pode utilizar esta funcionalidade para iniciar sessão nos serviços da Azure AD, como o Office 365. Insere-se no serviço utilizando a mesma palavra-passe que utiliza para iniciar sessão na sua instância de Diretório Ativo no local.

![O que é o Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

A sincronização de hash de palavra-passe ajuda reduzindo o número de senhas, os seus utilizadores precisam manter-se apenas uma. Sincronização de hash de palavra-passe pode:

* Melhore a produtividade dos seus utilizadores.
* Reduza os custos do seu helpdesk.  

Opcionalmente, pode configurar a sincronização de hash de palavra-passe como cópia de segurança se decidir utilizar a Federação com serviços da Federação de [Diretório Ativo (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) como o seu método de entrada.

Para utilizar a sincronização de hash de palavra-passe no seu ambiente, é necessário:

* Instale o Azure AD Connect.  
* Configure a sincronização do diretório entre a sua instância de Diretório Ativo no local e a sua instância de Diretório Ativo Azure.
* Ativar a sincronização de hash de senha.



Para mais informações, veja [o que é a identidade híbrida?](whatis-hybrid-identity.md)




## <a name="next-steps"></a>Passos Seguintes

- [O que é a identidade híbrida?](whatis-hybrid-identity.md)
- [O que são o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é a autenticação pass-through (PTA)?](how-to-connect-pta.md)
- [O que é a federação?](whatis-fed.md)
- [O que é um sinal único?](how-to-connect-sso.md)
- [Como funciona a sincronização do hash password](how-to-connect-password-hash-synchronization.md)
