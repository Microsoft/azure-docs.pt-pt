---
title: O que é a federação com a Azure AD? | Microsoft Docs
description: Descreve a federação com Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278757"
---
# <a name="what-is-federation-with-azure-ad"></a>O que é a federação com a Azure AD?

A Federação é uma coleção de domínios que estabeleceram confiança. O nível de confiança pode variar, mas normalmente inclui a autenticação e quase sempre inclui autorização. Uma federação típica pode incluir uma série de organizações que estabeleceram confiança para o acesso partilhado a um conjunto de recursos.

Você pode federar o seu ambiente no local com Azure AD e usar esta federação para autenticação e autorização.  Este método de início de sômtenção garante que toda a autenticação do utilizador ocorre no local.  Este método permite que os administradores implementem níveis mais rigorosos de controlo de acesso. Federação com FS AD e PingFederate está disponível.

![Identidade federada](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Se optar por utilizar a Federação com o Azure Active Directory Federation Services (AD FS), pode, facultativamente, configurar a sincronização de hash de palavras-passe como método alternativo, para o caso de a infraestrutura do AD FS falhar.


## <a name="next-steps"></a>Passos Seguintes

- [O que é a identidade híbrida?](./whatis-hybrid-identity.md)
- [O que são o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é a sincronização de hash de palavra-passe?](whatis-phs.md)
- [O que é a federação?](whatis-fed.md)
- [O que é um único sinal?](how-to-connect-sso.md)
- [Como funciona a federação](how-to-connect-fed-whatis.md)
- [Federação com o PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)