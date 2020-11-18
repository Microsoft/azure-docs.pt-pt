---
title: Verificação em duas etapas Azure AD MFA e ADFS - Diretório Ativo Azure
description: Esta é a página de autenticação multi-factor Azure AD que descreve como começar com Azure AD MFA e AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 247ebfa70de3e916894cccfc9374c4ddd4e761c2
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837945"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Começando com a autenticação multi-factor Azure AD e serviços da Federação de Diretórios Ativos

<center>

![Azure AD MFA e ADFS a começar](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se a sua organização federar o seu Ative Directory no local com o Azure Ative Directory usando AD FS, existem duas opções para a utilização da Autenticação Multi-Factor AD Azure.

* Recursos de nuvem seguro usando Azure AD Multi-Factor Authentication ou Ative Directory Federation Services
* Proteger recursos na nuvem e no local com o Servidor Multi-Factor Authentication do Azure

O quadro seguinte resume a experiência de verificação entre a garantia de recursos com a autenticação multi-factor AD Azure e a AD FS

| Experiência de Verificação - Aplicações baseadas no browser | Experiência de Verificação - Aplicações não baseadas no browser |
|:--- |:--- |
| Garantir recursos Azure AD utilizando a autenticação multi-factor Azure AD |<li>O primeiro passo de verificação é efetuado no local com o AD FS.</li> <li>O segundo passo é um método telefónico efetuado através da autenticação na nuvem.</li> |
| Proteger recursos do Azure AD com os Serviços de Federação do Active Directory |<li>O primeiro passo de verificação é efetuado no local com o AD FS.</li><li>O segundo passo é executado no local honrando a afirmação.</li> |

Advertências com palavras-passe de aplicação para utilizadores federados:

* As palavras-passe da aplicação são verificadas com a autenticação na nuvem, para que possam ignorar a federação. A federação apenas é utilizada ativamente ao definir uma palavra-passe de aplicação.
* As definições de Controlo de Acesso de Cliente no local não são honradas pelas palavras-passe de aplicações.
* Perde a capacidade de registos de autenticação no local para as palavras-passe de aplicações.
* A desativação/eliminação da conta pode demorar até três horas para a sincronização do diretório, atrasando a desativação/eliminação das palavras-passe das aplicações na identidade da nuvem.

Para obter informações sobre a configuração da autenticação multi-factor Azure AD ou do Servidor de Autenticação Multi-Factor Azure com FS AD, consulte os seguintes artigos:

* [Recursos de nuvem seguro usando autenticação multi-factor Azure AD e FS AD](howto-mfa-adfs.md)
* [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md)
* [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](howto-mfaserver-adfs-2.md)
