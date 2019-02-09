---
title: Ligar o Active Directory ao Azure Active Directory. | Microsoft Docs
description: O Azure AD Connect irá integrar os diretórios no local ao Azure Active Directory. Isto permite-lhe fornecer uma identidade comum para as aplicações do Office 365, do Azure e do SaaS integradas com o Azure AD.
keywords: introdução ao Azure AD Connect, descrição geral do Azure AD Connect, o que é o Azure AD Connect, instalar o Active Directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc468f328b8bd6066687996f5e74cfe867a4fc87
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2019
ms.locfileid: "55984648"
---
# <a name="what-is-hybrid-identity"></a>O que é a identidade híbrida? 

Hoje, as empresas e corporações estão se tornando cada vez mais uma mistura de locais e aplicações na cloud.  Os utilizadores necessitam de aceder a essas aplicações no local e na cloud. Este requisito se tornou um cenário um desafio. 

Soluções de identidade da Microsoft abrangem no local e de recursos com base na cloud.  Estas soluções criar uma identidade de utilizador comum para autenticação e autorização para todos os recursos, independentemente da localização. Chamamos a isto **identidade híbrida**.

Para obter a identidade híbrida, um dos três métodos de autenticação pode ser utilizado, dependendo dos cenários.   Os três métodos são: 

- **[Sincronização de hash de palavra-passe (PHS)](whatis-phs.md)**  
- **[Autenticação pass-through (PTA)](how-to-connect-pta.md)**  
- **[Federação](whatis-fed.md)** 

Estes métodos de autenticação também fornecem [início de sessão único](how-to-connect-sso.md) capacidades.  Início de sessão único assina automaticamente os utilizadores em quando estão nos respetivos dispositivos da empresa, ligados à sua rede empresarial.

Para obter mais informações, consulte [escolha o método de autenticação correta para sua solução de identidade híbrida do Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Cenários comuns e recomendações 

Seguem-se alguns cenários comuns de identidades híbridas e gestão de acessos com recomendações relativamente à opção (ou opções) de identidade híbrida que pode ser mais adequada a cada cenário. 

|Preciso de:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Sincronizar contas de utilizador, de contactos e de grupos novas criadas no Active Directory no local com a cloud automaticamente.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Configure o meu inquilino para cenários de híbrida do Office 365.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Ative os meus utilizadores iniciar sessão e aceder aos serviços em nuvem com a palavra-passe no local.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Implemente o início de sessão único utilizando as credenciais empresariais.|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendado](./media/whatis-hybrid-identity/ic195031.png) |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|  
|Certifique-se de que nenhum hashes de palavra-passe são armazenados na cloud.| |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Ative soluções de multi-factor authentication na cloud.| |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)|![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Ative soluções de autenticação multifator no local.| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Suporta a autenticação de smart card para os meus utilizadores. <sup>4</sup>| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 
|Apresentar as notificações de expiração de palavra-passe no Portal do Office e no ambiente de trabalho do Windows 10.| | |![Recomendado](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Sincronização de hash de palavras-passe com início de sessão único. 
> 
> <sup>2</sup> Autenticação pass-through e início de sessão único.  
> 
> <sup>3</sup> Início de sessão único federado com o AD FS.  
>  
> <sup>4</sup> O AD FS pode ser integrado no PKI da sua empresa, para permitir o início de sessão com certificados. Esses certificados podem ser certificados de software implementados através de canais de aprovisionamento fidedignos, como o MDM ou o GPO, ou certificados de smartcard (incluindo cartões PIV/CAC) ou Hello for Business (cert-trust). Para obter mais informações sobre o suporte da autenticação de smartcards, veja [este blogue](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="next-steps"></a>Próximos Passos 

- [O que é o Azure AD Connect e Connect Health?](whatis-azure-ad-connect.md) 
- [O que é a sincronização de hash de palavra-passe (PHS)?](whatis-phs.md) 
- [O que é a autenticação pass-through (PTA)?](how-to-connect-pta.md) 
- [O que é o Federação?](whatis-fed.md) 
- [O que é o início de sessão único em?](how-to-connect-sso.md) 

