---
title: O que é a identidade híbrida com o Azure Ative Directory?
description: A identidade híbrida está a ter uma identidade comum de utilizador para autenticação e autorização tanto no local como na nuvem.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "68779841"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>O que é a identidade híbrida com o Azure Ative Directory?

Hoje em dia, as empresas e as empresas estão a tornar-se cada vez mais uma mistura de aplicações no local e na nuvem.  Os utilizadores exigem o acesso a essas aplicações tanto no local como na nuvem. Gerir os utilizadores tanto no local como na nuvem coloca cenários desafiantes. 

As soluções identitárias da Microsoft estendem-se no local e nas capacidades baseadas na nuvem.  Estas soluções criam uma identidade comum de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Chamamos a isto **identidade híbrida.**

Com identidade híbrida para a Azure AD e gestão de identidade híbrida estes cenários tornam-se possíveis.

Para alcançar a identidade híbrida com a Azure AD, pode ser utilizado um dos três métodos de autenticação, dependendo dos seus cenários.   Os três métodos são: 

- **[Sincronização de hash de palavra-passe (PHS)](whatis-phs.md)**  
- **[Autenticação pass-through (PTA)](how-to-connect-pta.md)**  
- **[Federação (AD FS)](whatis-fed.md)** 

Estes métodos de autenticação também fornecem [um único sinal sobre](how-to-connect-sso.md) as capacidades.  O sinal único é que os seus utilizadores estão automaticamente ligados aos seus dispositivos corporativos, ligados à sua rede corporativa.

Para mais informações, consulte [Escolha o método de autenticação certo para a sua solução de identidade híbrida Azure Ative Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn). 

## <a name="common-scenarios-and-recommendations"></a>Cenários comuns e recomendações 

Seguem-se alguns cenários comuns de identidades híbridas e gestão de acessos com recomendações relativamente à opção (ou opções) de identidade híbrida que pode ser mais adequada a cada cenário. 

|Preciso de:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Sincronizar contas de utilizador, de contactos e de grupos novas criadas no Active Directory no local com a cloud automaticamente.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Instale o meu inquilino para o Escritório 365 cenários híbridos.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Permitir que os meus utilizadores acedam e acedam aos serviços na nuvem utilizando a sua senha no local.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementar um único sinal usando credenciais corporativas.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|  
|Certifique-se de que não existem hashes de senha na nuvem.| |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Ative soluções de autenticação multifactor baseadas na nuvem.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Ativar no local soluções de autenticação multifactor.| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Apoie a autenticação do smartcard para os meus utilizadores. <sup>4</sup>| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Apresentar notificações de validade da palavra-passe no Portal do Escritório e no ambiente de trabalho do Windows 10.| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Sincronização de hash de palavras-passe com início de sessão único. 
> 
> <sup>2</sup> Autenticação pass-through e início de sessão único.  
> 
> <sup>3</sup> Início de sessão único federado com o AD FS.  
>  
> <sup>4</sup> O AD FS pode ser integrado no PKI da sua empresa, para permitir o início de sessão com certificados. Esses certificados podem ser certificados de software implementados através de canais de aprovisionamento fidedignos, como o MDM ou o GPO, ou certificados de smartcard (incluindo cartões PIV/CAC) ou Hello for Business (cert-trust). Para obter mais informações sobre o suporte da autenticação de smartcards, veja [este blogue](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisitos de licença para utilização do Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Passos Seguintes 

- [O que são o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md) 
- [O que é a sincronização de hash (PHS)?](whatis-phs.md) 
- [O que é a autenticação pass-through (PTA)?](how-to-connect-pta.md) 
- [O que é a federação?](whatis-fed.md) 
- [O que é um sinal único?](how-to-connect-sso.md) 

