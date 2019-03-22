---
title: Autenticação de utilizador do Active Directory do Azure - Azure Active Directory
description: Como administrador do Azure AD, como posso proteger a autenticação do utilizador ao reduzir o impacto no utilizador final?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38bc2f138a8e56fc56b48ccdaf0636a93138f95d
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314659"
---
# <a name="what-methods-are-available-for-authentication"></a>Que métodos de autenticação estão disponíveis?

Ouvimos nas notícias que as palavras-passe estão a ser roubadas e as identidades comprometidas. Um segundo fator além de uma palavra-passe aumenta imediatamente a segurança da sua organização. O Microsoft Azure Active Directory (Azure AD) inclui funcionalidades, como o Multi-Factor Authentication do Azure (MFA do Azure) e a reposição personalizada de palavra-passe (SSPR) do Azure AD, para ajudar os administradores a proteger as suas organizações e utilizadores com métodos de autenticação adicionais.

Há muitos cenários que incluem: a iniciar sessão a uma aplicação, repor a palavra-passe, permitindo que o Windows Hello entre outras, os utilizadores podem ser solicitados para fornecer verificação adicional que são quem dizem que eles são.

A verificação adicional pode ser na forma de métodos de autenticação, como:

* Um código fornecido numa mensagem de texto ou e-mail
* Uma chamada telefónica
* Uma notificação ou código no telemóvel
* Respostas às perguntas de segurança

![Página de início de sessão login.microsoftonline.com de exemplo no Chrome](media/overview-authentication/overview-login.png)

O MFA do Azure e a reposição personalizada de palavra-passe do Azure AD permitem aos administradores controlar a configuração, política, monitorização e relatórios com o Azure AD e o portal do Azure para proteger as suas organizações.

## <a name="self-service-password-reset"></a>Reposição personalizada de palavra-passe

A reposição personalizada de palavra-passe permite aos utilizadores repor a palavra-passe, sem qualquer intervenção do administrador, quando e onde precisarem.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

A reposição personalizada de palavra-passe inclui:

* **Alteração de palavra-passe:** Eu sei a minha palavra-passe, mas quiser alterá-lo para algo novo.
* **Reposição de palavra-passe:** Não é possível iniciar sessão e pretende repor a palavra-passe através de um ou mais métodos de autenticação aprovada.
* **Desbloqueio de conta:** Eu não consigo iniciar sessão porque a minha conta está bloqueada e quero desbloquear através de um ou mais métodos de autenticação aprovada.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Ao utilizar métodos de autenticação aprovados pelo administrador, o MFA do Azure ajuda a salvaguardar o acesso a dados e aplicações, enquanto cumpre a exigência de um processo de início de sessão simples.

## <a name="next-steps"></a>Passos Seguintes

O passo seguinte é configurar a reposição personalizada de palavra-passe e o Multi-Factor Authentication do Azure.

Para começar a utilizar a reposição personalizada de palavra-passe, veja o artigo de início rápido [Ativar a SSPR](quickstart-sspr.md).

Saiba mais sobre senhas de auto-atendimento Repor no artigo, [como ele funciona: Redefinição de senha de autoatendimento de AD do Azure](concept-sspr-howitworks.md)

Saiba mais sobre o Azure multi-factor Authentication no artigo, [como ele funciona: Azure Multi-Factor Authentication](concept-mfa-howitworks.md) (Como funciona: Multi-Factor Authentication do Azure)
