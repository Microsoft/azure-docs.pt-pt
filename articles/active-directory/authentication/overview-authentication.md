---
title: Autenticação do utilizador do Azure Active Directory
description: Como administrador do Azure AD, como posso proteger a autenticação do utilizador ao reduzir o impacto no utilizador final?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: overview
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 0b232ed8bacfeb896fd5ee6ff9e2a58b71dc1517
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162976"
---
# <a name="what-methods-are-available-for-authentication"></a>Que métodos de autenticação estão disponíveis?

Ouvimos nas notícias que as palavras-passe estão a ser roubadas e as identidades comprometidas. Um segundo fator além de uma palavra-passe aumenta imediatamente a segurança da sua organização. O Microsoft Azure Active Directory (Azure AD) inclui funcionalidades, como o Multi-Factor Authentication do Azure (MFA do Azure) e a reposição personalizada de palavra-passe (SSPR) do Azure AD, para ajudar os administradores a proteger as suas organizações e utilizadores com métodos de autenticação adicionais.

Quando um utilizador precisar de aceder a aplicações confidenciais, repor a palavra-passe ou ativar o Windows Hello, pode ser-lhe solicitado para fornecer verificação adicional da sua identidade.

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

* **Alteração da palavra-passe:** sei qual é a minha palavra-passe, mas quero alterá-la para uma nova.
* **Reposição da palavra-passe:** não consigo iniciar sessão e quero repor a minha palavra-passe através de um ou mais métodos de autenticação aprovados.
* **Desbloqueio da conta:** não consigo iniciar sessão porque a minha conta está bloqueada e quero desbloqueá-la através de um ou mais métodos de autenticação aprovados.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

A Azure Multi-Factor Authentication (MFA) é uma solução de verificação em dois passos da Microsoft. Ao utilizar métodos de autenticação aprovados pelo administrador, o MFA do Azure ajuda a salvaguardar o acesso a dados e aplicações, enquanto cumpre a exigência de um processo de início de sessão simples.

## <a name="next-steps"></a>Passos seguintes

O passo seguinte é configurar a reposição personalizada de palavra-passe e o Multi-Factor Authentication do Azure.

Para começar a utilizar a reposição personalizada de palavra-passe, veja o artigo de início rápido [Ativar a SSPR](quickstart-sspr.md).

Saiba mais sobre a reposição personalizada de palavra-passe no artigo, [Como funciona: reposição personalizada de palavra-passe do Azure AD](concept-sspr-howitworks.md)

Saiba mais sobre o Multi-Factor Authentication do Azure no artigo [Como funciona: Multi-Factor Authentication do Azure](concept-mfa-howitworks.md)