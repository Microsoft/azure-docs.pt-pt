---
title: O que é a identidade híbrida com Azure Active Directory?
description: A identidade híbrida está tendo uma identidade de usuário comum para autenticação e autorização locais e na nuvem.
keywords: introdução ao Azure AD Connect, descrição geral do Azure AD Connect, o que é o Azure AD Connect, instalar o Active Directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3d681dd06f07f6174e31b59cccf42df5dc16a1e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779841"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>O que é a identidade híbrida com Azure Active Directory?

Hoje, as empresas e as empresas estão se tornando cada vez mais uma combinação de aplicativos locais e na nuvem.  Os utilizadores necessitam de aceder a essas aplicações no local e na cloud. O gerenciamento de usuários locais e na nuvem representa cenários desafiadores. 

Soluções de identidade da Microsoft abrangem no local e de recursos com base na cloud.  Estas soluções criar uma identidade de utilizador comum para autenticação e autorização para todos os recursos, independentemente da localização. Chamamos a isto **identidade híbrida**.

Com a identidade híbrida para o Azure AD e o gerenciamento de identidade híbrida, esses cenários tornam-se possíveis.

Para obter a identidade híbrida com o Azure AD, um dos três métodos de autenticação pode ser usado, dependendo dos seus cenários.   Os três métodos são: 

- **[Sincronização de hash de palavra-passe (PHS)](whatis-phs.md)**  
- **[Autenticação pass-through (PTA)](how-to-connect-pta.md)**  
- **[Federação (AD FS)](whatis-fed.md)** 

Estes métodos de autenticação também fornecem [início de sessão único](how-to-connect-sso.md) capacidades.  Início de sessão único assina automaticamente os utilizadores em quando estão nos respetivos dispositivos da empresa, ligados à sua rede empresarial.

Para obter mais informações, consulte [escolha o método de autenticação correta para sua solução de identidade híbrida do Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). 

## <a name="common-scenarios-and-recommendations"></a>Cenários comuns e recomendações 

Seguem-se alguns cenários comuns de identidades híbridas e gestão de acessos com recomendações relativamente à opção (ou opções) de identidade híbrida que pode ser mais adequada a cada cenário. 

|Preciso de:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Sincronizar contas de utilizador, de contactos e de grupos novas criadas no Active Directory no local com a cloud automaticamente.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Configure meu locatário para cenários híbridos do Office 365.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Permita que meus usuários entrem e acessem serviços de nuvem usando sua senha local.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Implemente o logon único usando credenciais corporativas.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|  
|Certifique-se de que nenhum hash de senha seja armazenado na nuvem.| |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilite soluções de autenticação multifator baseadas em nuvem.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilitar soluções de autenticação multifator locais.| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Suporte à autenticação de cartão inteligente para meus usuários. <sup>4</sup>| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Exibir notificações de expiração de senha no portal do Office e na área de trabalho do Windows 10.| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Sincronização de hash de palavras-passe com início de sessão único. 
> 
> <sup>2</sup> Autenticação pass-through e início de sessão único.  
> 
> <sup>3</sup> Início de sessão único federado com o AD FS.  
>  
> <sup>4</sup> O AD FS pode ser integrado no PKI da sua empresa, para permitir o início de sessão com certificados. Esses certificados podem ser certificados de software implementados através de canais de aprovisionamento fidedignos, como o MDM ou o GPO, ou certificados de smartcard (incluindo cartões PIV/CAC) ou Hello for Business (cert-trust). Para obter mais informações sobre o suporte da autenticação de smartcards, veja [este blogue](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisitos de licença para usar o Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Próximos Passos 

- [O que é o Azure AD Connect e Connect Health?](whatis-azure-ad-connect.md) 
- [O que é a sincronização de hash de palavra-passe (PHS)?](whatis-phs.md) 
- [O que é a autenticação pass-through (PTA)?](how-to-connect-pta.md) 
- [O que é o Federação?](whatis-fed.md) 
- [O que é o início de sessão único em?](how-to-connect-sso.md) 

