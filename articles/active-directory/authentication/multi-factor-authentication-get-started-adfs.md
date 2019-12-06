---
title: Verificação em duas etapas Azure MFA e ADFS-Azure Active Directory
description: Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fbbba49ddf2252e22cb32a0b8adc6fa2070e999
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847138"
---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Introdução ao Multi-Factor Authentication do Azure e aos Serviços de Federação do Active Directory

<center>

![introdução ao Azure MFA e ao ADFS](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se a sua organização tiver federado o seu Active Directory no local com o Azure Active Directory através do AD FS, existem duas opções para utilizar o Multi-Factor Authentication do Azure.

* Proteger recursos da nuvem com o Multi-Factor Authentication do Azure ou os Serviços de Federação do Active Directory
* Proteger recursos na nuvem e no local com o Servidor Multi-Factor Authentication do Azure

A tabela seguinte resume a experiência de verificação entre proteger recursos com o Multi-Factor Authentication do Azure e o AD FS

| Experiência de Verificação - Aplicações baseadas no browser | Experiência de Verificação - Aplicações não baseadas no browser |
|:--- |:--- |
| Proteger recursos do Azure AD com o Multi-Factor Authentication do Azure |<li>O primeiro passo de verificação é efetuado no local com o AD FS.</li> <li>O segundo passo é um método telefónico efetuado através da autenticação na nuvem.</li> |
| Proteger recursos do Azure AD com os Serviços de Federação do Active Directory |<li>O primeiro passo de verificação é efetuado no local com o AD FS.</li><li>O segundo passo é executado no local honrando a afirmação.</li> |

Advertências com palavras-passe de aplicação para utilizadores federados:

* As palavras-passe da aplicação são verificadas com a autenticação na nuvem, para que possam ignorar a federação. A federação apenas é utilizada ativamente ao definir uma palavra-passe de aplicação.
* As definições de Controlo de Acesso de Cliente no local não são honradas pelas palavras-passe de aplicações.
* Perde a capacidade de registos de autenticação no local para as palavras-passe de aplicações.
* A desativação/eliminação da conta pode demorar até três horas para a sincronização do diretório, atrasando a desativação/eliminação das palavras-passe das aplicações na identidade da nuvem.

Para obter informações sobre como configurar o Multi-Factor Authentication do Azure ou o Servidor Multi-Factor Authentication do Azure com o AD FS, veja os seguintes artigos:

* [Proteger recursos da nuvem com o Multi-Factor Authentication do Azure e o AD FS](howto-mfa-adfs.md)
* [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md)
* [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](howto-mfaserver-adfs-2.md)
