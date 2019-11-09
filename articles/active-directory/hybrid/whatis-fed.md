---
title: O que é Federação com o Azure AD? | Microsoft Docs
description: Descreve a Federação com o Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeb8886987c09b504262d6ea12863a646d6022cc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883651"
---
# <a name="what-is-federation-with-azure-ad"></a>O que é Federação com o Azure AD?

A Federação é uma coleção de domínios que estabeleceram confiança. O nível de confiança pode variar, mas normalmente inclui autenticação e quase sempre inclui autorização. Uma federação típica pode incluir várias organizações que estabeleceram confiança para acesso compartilhado a um conjunto de recursos.

Você pode federar seu ambiente local com o Azure AD e usar essa Federação para autenticação e autorização.  Esse método de entrada garante que toda a autenticação do usuário ocorra localmente.  Esse método permite que os administradores implementem níveis mais rigorosos de controle de acesso. A Federação com AD FS e PingFederate está disponível.

![Identidade federada](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Se optar por utilizar a Federação com o Azure Active Directory Federation Services (AD FS), pode, facultativamente, configurar a sincronização de hash de palavras-passe como método alternativo, para o caso de a infraestrutura do AD FS falhar.


## <a name="next-steps"></a>Passos Seguintes

- [O que é a identidade híbrida?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
- [O que é o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é a sincronização de hash de senha?](whatis-phs.md)
- [O que é Federação?](whatis-fed.md)
- [O que é logon único?](how-to-connect-sso.md)
- [Como a Federação funciona](how-to-connect-fed-whatis.md)
- [Federação com PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
